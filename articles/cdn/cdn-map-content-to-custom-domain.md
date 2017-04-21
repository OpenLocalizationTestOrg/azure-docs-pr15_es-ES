<properties
     pageTitle="Cómo asignar contenido en Azure entrega de contenido (CDN) de la red a un dominio personalizado | Microsoft Azure"
     description="En este tema se muestra cómo asignar un contenido CDN a un dominio personalizado."
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
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Cómo asignar dominio personalizado al extremo de la red de entrega de contenido (CDN)
Puede asignar un dominio personalizado a un extremo CDN para poder usar su propio nombre de dominio en las direcciones URL para el contenido de la caché en lugar de utilizar un subdominio del azureedge.net.

Hay dos formas de mapa de su dominio personalizado a un extremo CDN:

1. [Crear un registro CNAME con el registrador de dominios y asignar su dominio personalizado y un subdominio al extremo CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Un registro CNAME es una característica DNS que asigna un dominio de origen, como `www.contosocdn.com` o `cdn.contoso.com`, a un dominio de destino. En este caso, el dominio de origen es un dominio personalizado y el subdominio (un subdominio, como **www** o **cdn** siempre es necesario). El dominio de destino es el extremo CDN.  

    El proceso de asignación de su dominio personalizado a su extremo CDN, sin embargo, podría un breve período de tiempo de inactividad del dominio mientras se va a registrar el dominio en el Portal de Azure.

2. [Agregar un paso intermedio con **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Si el dominio personalizado admite actualmente una aplicación con un contrato de nivel de servicio (SLA) que no requiere que haya ningún tiempo de inactividad, puede usar el subdominio Azure **cdnverify** proporcionar un paso intermedio de registro para que los usuarios podrán obtener acceso a su dominio mientras el DNS asignación tiene lugar.  

Después de registrar su dominio personalizado con uno de los procedimientos anteriores, desea [comprobar que el subdominio personalizado hace referencia el extremo CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Debe crear un registro CNAME con el registrador de dominios para asignar el dominio al extremo CDN. Registros CNAME asignan subdominios específicos como `www.contoso.com` o `cdn.contoso.com`. No es posible asignar un registro CNAME para un dominio raíz, como por ejemplo `contoso.com`.
>    
> Un subdominio solo se puede asociar con un extremo CDN. El registro CNAME que cree redirige todo el tráfico dirigido al subdominio al extremo especificado.  Por ejemplo, si asocia `www.contoso.com` con su extremo CDN, no asocia con otros extremos de Azure, como un extremo de la cuenta de almacenamiento o un extremo de servicio de nube. Sin embargo, puede usar diferentes subdominios desde el mismo dominio para los extremos de servicio diferente. También puede asignar diferentes subdominios al mismo extremo CDN.
>
> Para extremos de **Azure CDN de Verizon** (Standard y Premium), tenga en cuenta que se tarda en **90 minutos** para que los cambios de dominio personalizado en propagarse a nodos de borde CDN.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrar un dominio personalizado para un extremo CDN de Azure

1.  Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2.  Haga clic en **Examinar**, luego **CDN perfiles**y, a continuación, en el perfil CDN con el extremo que desea asignar a un dominio personalizado.  
3.  En el módulo **CDN perfil** , haga clic en el extremo CDN con la que desea asociar el subdominio.
4.  En la parte superior de la hoja de extremo, haga clic en el botón **Agregar un dominio personalizado** .  En el módulo de **Agregar un dominio personalizado** , verá el nombre de host de extremo derivado de su extremo CDN, usar para crear un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como ** &lt;EndpointName >. azureedge.net**.  Puede copiar el nombre de host a utilizar para crear el registro CNAME.  
5.  Vaya al sitio de web del registrador y busque la sección para crear registros DNS. Es posible en una sección como **Nombre de dominio**, **DNS**o **Nombre de servidor de administración**.
6.  Busque la sección de administración de CNAME. Debe ir a una página de configuración avanzada y busque las palabras CNAME, Alias o subdominios.
7.  Crear un nuevo registro CNAME que asigna el subdominio elegido (por ejemplo, **www** o **cdn**) para el nombre de host proporcionado en el módulo de **Agregar un dominio personalizado** .
8.  Volver a la hoja de **Agregar un dominio personalizado** y escriba su dominio personalizado, incluidos el subdominio, en el cuadro de diálogo. Por ejemplo, escriba el nombre de dominio en el formato `www.contoso.com` o `cdn.contoso.com`.   

    Azure comprobar que existe el registro CNAME para el nombre de dominio que ha escrito. Si el registro CNAME es correcto, se valida el dominio personalizado.  Para extremos de **Azure CDN de Verizon** (Standard y Premium), puede tardar hasta 90 minutos para propagarse a todos los nodos de borde CDN, pero la configuración de dominio personalizado.  

    Tenga en cuenta que en algunos casos puede tardar tiempo para el registro CNAME en propagarse a los servidores DNS de Internet. Si el dominio no se valida inmediatamente y, a continuación, cree que el registro CNAME es correcto, espere unos minutos y vuelva a intentarlo.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrar un dominio personalizado para un extremo de Azure CDN mediante el subdominio intermediario cdnverify  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar**, luego **CDN perfiles**y, a continuación, en el perfil CDN con el extremo que desea asignar a un dominio personalizado.  
3. En el módulo **CDN perfil** , haga clic en el extremo CDN con la que desea asociar el subdominio.
4. En la parte superior de la hoja de extremo, haga clic en el botón **Agregar un dominio personalizado** .  En el módulo de **Agregar un dominio personalizado** , verá el nombre de host de extremo derivado de su extremo CDN, usar para crear un nuevo registro CNAME. El formato de la dirección del nombre de host aparecerá como ** &lt;EndpointName >. azureedge.net**.  Puede copiar el nombre de host a utilizar para crear el registro CNAME.
5. Vaya al sitio de web del registrador y busque la sección para crear registros DNS. Es posible en una sección como **Nombre de dominio**, **DNS**o **Nombre de servidor de administración**.
6. Busque la sección de administración de CNAME. Debe ir a una página de configuración avanzada y busque las palabras **CNAME**, **Alias**o **subdominios**.
7. Crear un nuevo registro CNAME y proporcione un alias de subdominio que incluye el subdominio **cdnverify** . Por ejemplo, el subdominio que especifique estarán en el formato **cdnverify.www** o **cdnverify.cdn**. A continuación, proporcione el nombre de host, que es el extremo CDN, en el formato **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Volver a la hoja de **Agregar un dominio personalizado** y escriba su dominio personalizado, incluidos el subdominio, en el cuadro de diálogo. Por ejemplo, escriba el nombre de dominio en el formato `www.contoso.com` o `cdn.contoso.com`. Tenga en cuenta que en este paso, no debe comenzar el subdominio con **cdnverify**.  

    Azure comprobar que existe el registro CNAME para el nombre de dominio cdnverify que ha escrito.
9. En este momento, se ha comprobado su dominio personalizado haciendo Azure, pero el tráfico a su dominio aún no se enruta a su extremo CDN. Después de espera tiempo suficiente para permitir la configuración de dominio personalizado para propagarse a los nodos de borde CDN (90 minutos para **Azure CDN de Verizon**, 1-2 minutos para **Azure CDN de Akamai**), volver al sitio web de tu entidad de registro DNS y crear otro registro CNAME asigna el subdominio a su extremo CDN. Por ejemplo, especifique el subdominio como **www** o **cdn**y el nombre de host como ** &lt;EndpointName >. azureedge.net**. Con este paso, el registro del dominio personalizado es completo.
10. Por último, puede eliminar el registro CNAME creado usando **cdnverify**, tal y como era necesaria sólo como un paso intermedio.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Compruebe que el subdominio personalizado hace referencia el extremo CDN

- Después de completar el registro del dominio personalizado, puede obtener acceso a contenido que se almacena en caché en el extremo CDN con el dominio personalizado.
En primer lugar, asegúrese de que tiene contenido público que se almacena en el extremo. Por ejemplo, si su extremo CDN está asociado a una cuenta de almacenamiento, la CDN almacena contenido en los contenedores de blob público. Para comprobar el dominio personalizado, asegúrese de que el contenedor está configurado para permitir el acceso del público y que contiene al menos un blob.
- En el explorador, vaya a la dirección del blob con el dominio personalizado. Por ejemplo, si su dominio personalizado es `cdn.contoso.com`, la dirección URL de un blob en caché será similar a la siguiente URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Vea también

[Cómo habilitar la red de entrega de contenido (CDN) para Azure](./cdn-create-new-endpoint.md)  

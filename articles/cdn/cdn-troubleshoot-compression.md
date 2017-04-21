<properties
    pageTitle="Solución de problemas de compresión de archivos en Azure CDN | Microsoft Azure"
    description="Solucionar problemas con la compresión de archivos CDN de Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-file-compression"></a>Solución de problemas de compresión de archivos CDN

Este artículo le ayudará a solucionar problemas con [la compresión de archivos CDN](cdn-improve-performance.md).

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede abrir un incidente de soporte de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

## <a name="symptom"></a>Síntoma

Está habilitada la compresión para el extremo, pero se devuelven archivos sin comprimir.

>[AZURE.TIP] Para comprobar que si se devuelven los archivos comprimidos, debe usar una herramienta como [Fiddler](http://www.telerik.com/fiddler) o [Herramientas de desarrollo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de su explorador.  Encabezados de respuesta de verificación HTTP devuelven la caché CDN contenidos.  Si hay un encabezado denominado `Content-Encoding` con un valor de **gzip**, **bzip2**o **disminuir**, su contenido se comprime.
>
>![Encabezado de codificación de contenido](./media/cdn-troubleshoot-compression/cdn-content-header.png)

## <a name="cause"></a>Causa

Hay varias causas posibles, incluidos:

- El contenido solicitado no es elegible para la compresión.
- Compresión no está habilitada para el tipo de archivo solicitado.
- La solicitud HTTP no incluía un encabezado que solicita un tipo de compresión válidos.

## <a name="troubleshooting-steps"></a>Pasos de solución de problemas

> [AZURE.TIP] Como con la implementación de nuevos extremos, cambios de configuración de CDN tardan algún tiempo en propagarse a través de la red.  Normalmente, los cambios se aplican en 90 minutos.  Si es la primera vez que se ha configurado la compresión para el extremo CDN, considere la posibilidad de espera horas 1 y 2 para comprobar la compresión configuración se propaga a los POP. 

### <a name="verify-the-request"></a>Compruebe la solicitud

En primer lugar, se debe hacer una comprobación rápida de la solicitud.  Puede usar [Herramientas de desarrollo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de su explorador para ver las solicitudes que se realizan.

- Compruebe la solicitud se envía a la dirección URL del extremo, `<endpointname>.azureedge.net`y no el origen.
- Compruebe la solicitud contiene un encabezado de **Codificación de Aceptar** y, a continuación, el valor para el encabezado contiene **gzip**, **disminuir**o **bzip2**.

> [AZURE.NOTE] Perfiles de **Azure CDN de Akamai** solo admiten codificación **gzip** .

![Encabezados de la solicitud CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Comprobar la configuración de compresión (perfil CDN estándar)

> [AZURE.NOTE] Este paso solo se aplica si su perfil CDN es un **Azure CDN estándar de Verizon** o **Azure CDN estándar de Akamai** . 

Desplácese hasta el extremo en el [portal de Azure](https://portal.azure.com) y haga clic en el botón **Configurar** .

- Compruebe que está habilitada la compresión.
- Compruebe el tipo MIME para el contenido se comprime está incluido en la lista de formatos comprimidos.

![Configuración de compresión CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Comprobar la configuración de compresión (perfil CDN Premium)

> [AZURE.NOTE] Este paso solo se aplica si su perfil CDN es un **Azure CDN Premium de Verizon** .

Desplácese hasta el extremo en el [portal de Azure](https://portal.azure.com) y haga clic en el botón **Administrar** .  Se abrirá el portal adicional.  Desplace el puntero sobre la pestaña **HTTP grandes** , a continuación, mantenga el mouse sobre el elemento de **Configuración de la caché** .  Haga clic en **Comprimir**. 

- Compruebe que está habilitada la compresión.
- Compruebe que la lista de **Tipos de archivo** contiene una lista de valores separados por comas (sin espacios) de los tipos de MIME.
- Compruebe el tipo MIME para el contenido se comprime está incluido en la lista de formatos comprimidos.

![Configuración de compresión de CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Comprobar que el contenido está en caché

> [AZURE.NOTE] Este paso solo se aplica si su perfil CDN es un perfil de **Azure CDN de Verizon** (estándar o Premium).

Con herramientas de desarrollo de su explorador, compruebe los encabezados de respuesta para garantizar que el archivo se almacena en la región donde se solicita.

- Compruebe el encabezado de respuesta del **servidor** .  El encabezado debe tener el formato de **plataforma (Id. de POP/servidor)**, tal como se muestra en el ejemplo siguiente.
- Compruebe el encabezado de respuesta de la **Caché de X** .  El encabezado debe leer **visitas**.  

![Encabezados de respuesta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Compruebe que el archivo cumple los requisitos de tamaño

> [AZURE.NOTE] Este paso solo se aplica si su perfil CDN es un perfil de **Azure CDN de Verizon** (estándar o Premium).

Para ser elegible para la compresión, un archivo debe cumplir los siguientes requisitos de tamaño:

- Mayor de 128 bytes.
- Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Compruebe la solicitud en el servidor de origen de un encabezado **a través de**

El encabezado **a través de** HTTP se indica en el servidor web que la solicitud se pasa por un servidor proxy.  Los servidores web Microsoft IIS predeterminada no comprimir respuestas cuando la solicitud contiene un encabezado **a través de** .  Para reemplazar este comportamiento, realice lo siguiente:

- **IIS 6**: [establecer HcNoCompressionForProxies = "FALSE" en las propiedades de Metabase IIS](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 y hasta**: [establecer **noCompressionForHttp10** y **noCompressionForProxies** en False en la configuración del servidor](http://www.iis.net/configreference/system.webserver/httpcompression)


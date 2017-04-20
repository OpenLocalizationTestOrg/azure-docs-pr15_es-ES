<properties
    pageTitle="Solución de problemas de los extremos de Azure CDN devolver estado 404 | Microsoft Azure"
    description="Solucionar problemas de 404 códigos de respuesta con extremos CDN de Azure."
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
    ms.date="07/28/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Solución de problemas de los extremos CDN devolver 404 Estados

Este artículo le ayudará a solucionar problemas con [los extremos CDN](cdn-create-new-endpoint.md) devolver 404 errores.

Si necesita más ayuda en cualquier punto de este artículo, póngase en contacto con los expertos de Azure en [Azure de MSDN y los foros de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede abrir un incidente de soporte de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.

## <a name="symptom"></a>Síntoma

Ha creado un perfil CDN y un punto final, pero el contenido no están disponibles en la CDN.  Los usuarios que intenten tener acceso a su contenido a través de la dirección URL de CDN recibirán códigos de estado HTTP 404. 

## <a name="cause"></a>Causa

Hay varias causas posibles, incluidos:

- Origen del archivo no será visible para la CDN
- El punto final está mal configurado, provocando la CDN buscar en el lugar adecuado
- El host está rechazando el encabezado de host de la CDN
- El punto final no ha tenido tiempo en propagarse la CDN

## <a name="troubleshooting-steps"></a>Pasos de solución de problemas

> [AZURE.IMPORTANT] Después de crear un extremo CDN, no inmediatamente estará disponible para su uso, como el tiempo para el registro en propagarse a través de la CDN.  Perfiles de <b>Azure CDN de Akamai</b> propagación normalmente se completa dentro de un minuto.  Perfiles de <b>Azure CDN de Verizon</b> , propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más.  Si completar los pasos de este documento y sigue recibiendo 404 respuestas, considere la posibilidad de esperar unas pocas horas para comprobar nuevamente antes de abrir una incidencia de soporte técnico.

### <a name="check-the-origin-file"></a>Compruebe el archivo de origen

En primer lugar, debemos comprobemos el que el archivo queremos en caché está disponible en nuestro origen y es accesible públicamente.  La forma más rápida de hacerlo es abrir un explorador en una tabla en privado o sesión de incógnito y busque el archivo.  Simplemente escriba o pegue la dirección URL en el cuadro Dirección y ver si da como resultado en el archivo de que lo esperado.  En este ejemplo, voy a usar un archivo en una cuenta de almacenamiento de Azure, accesible en `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Como puede ver, pasa correctamente la prueba.

![¡Éxito!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Aunque esta es la manera más rápida y sencilla para comprobar que el archivo esté disponible públicamente, algunas configuraciones de red de su organización podrían dar la impresión de que este archivo esté disponible públicamente cuando es, de hecho, sólo visible para los usuarios de su red (incluso si está hospedado en Azure).  Si tiene un explorador externo desde el que puede probar, por ejemplo, un dispositivo móvil que no está conectado a la red de su organización o una máquina virtual en Azure, que sería mejor.

### <a name="check-the-origin-settings"></a>Compruebe la configuración de origen

Ahora que ya hemos verificado que el archivo esté disponible públicamente en internet, debemos comprobemos nuestra configuración de origen.  En el [Portal de Azure](https://portal.azure.com), vaya a su perfil CDN y haga clic en el extremo que está solución de problemas.  En el módulo de **extremo** resultante, haga clic en el origen.  

![Módulo de extremo con origin resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Aparece el módulo de **origen** . 

![Módulo de origen](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Nombre de host y el tipo de origen

Compruebe que el **tipo de origen** es correcto y el **nombre de host de origen**.  En mi ejemplo, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, la parte del nombre de host de la dirección URL es `cdndocdemo.blob.core.windows.net`.  Como puede ver en la captura de pantalla, esto es correcto.  Almacenamiento de Azure, aplicación Web y orígenes de servicio de nube, el campo **nombre de host de origen** es una lista desplegable, por lo que no tienes que preocuparte escribe correctamente.  Sin embargo, si está usando un origen personalizado, es *fundamental* que el nombre de host está escrito correctamente.

#### <a name="http-and-https-ports"></a>Puertos HTTP y HTTPS

Otro lo que Active esta opción es los **puertos** **HTTP** y HTTPS.  En la mayoría de los casos, 80 y 443 son correctos y no requieren cambios.  Sin embargo, si el servidor de origen está escuchando en un puerto diferente, que tendrán que estar representado aquí.  Si no está seguro, solo tiene que buscar en la dirección URL para el archivo de origen.  Especificaciones de HTTP y HTTPS especifican los puertos 80 y 443 como los valores predeterminados. En la dirección URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, no se especifica un puerto, por lo que se asume el valor predeterminado de 443 y la configuración es correcta.  

Sin embargo, diga la dirección URL para el archivo de origen que probó anteriormente es `http://www.contoso.com:8080/file.txt`.  Nota la `:8080` al final del segmento de host.  Que indica al explorador que utilizan el puerto `8080` para conectarse al servidor web en `www.contoso.com`, así que deberá especificar 8080 en el campo **puerto HTTP** .  Es importante que tenga en cuenta que estas opciones de configuración de puerto sólo afectan a qué puerto el extremo se usa para recuperar información de origen.

> [AZURE.NOTE] Extremos de **Azure CDN de Akamai** no permitir el intervalo de puertos TCP completo para orígenes distintos.  Para obtener una lista de puertos de origen no permitidos, vea [Azure CDN de puertos de origen de Akamai permitidos](https://msdn.microsoft.com/library/mt757337.aspx).  
  
### <a name="check-the-endpoint-settings"></a>Compruebe la configuración de punto final

En el módulo de **punto final** , haga clic en el botón **Configurar** .

![Módulo de extremo con el botón de configurar resaltado](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Aparece el módulo de **Configurar** del extremo.

![Configurar el módulo](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolos

Para **los protocolos**, compruebe que está seleccionado el protocolo utilizado por los clientes.  El mismo protocolo usado por el cliente será la utilizada para obtener acceso al origen, por lo que es importante tener los puertos de origen configurados correctamente en la sección anterior.  El extremo sólo escucha en el predeterminado HTTP y HTTPS los puertos 80 y 443, independientemente de los puertos de origen.

Volvamos a nuestro ejemplo hipotética con `http://www.contoso.com:8080/file.txt`.  Como sea capaz de recordar, Contoso especificado `8080` como su HTTP puerto, pero supongamos también especifica `44300` como su puerto HTTPS.  Si ha creado un extremo denominado `contoso`, su nombre de host de extremo CDN sería `contoso.azureedge.net`.  Una solicitud de `http://contoso.azureedge.net/file.txt` es una solicitud HTTP, por lo que el extremo usaría HTTP en el puerto 8080 para recuperarla desde el origen.  Una solicitud segura a través de HTTPS, `https://contoso.azureedge.net/file.txt`, haría que el punto final para que use HTTPS en el puerto 44300 cuando al recuperar el archivo desde el origen.

#### <a name="origin-host-header"></a>Encabezado de host de origen

El **encabezado de host de origen** es el valor de encabezado de host enviado al origen con cada solicitud.  En la mayoría de los casos, debería el mismo que el **nombre de host de origen** se revisarán anteriormente.  Un valor incorrecto en este campo generalmente no provocar 404 Estados, pero es probable que se produzcan otros Estados 4xx, dependiendo de lo que espera el origen.

#### <a name="origin-path"></a>Ruta de acceso de origen

Por último, hemos debemos comprobar nuestra **ruta de acceso de origen**.  De forma predeterminada está en blanco.  Solo debe usar este campo si desea limitar el ámbito de los recursos hospedado de origen que desea que estén disponibles en la CDN.  

Por ejemplo, en Mi extremo quería todos los recursos en mi cuenta de almacenamiento para que esté disponible, de modo que deja de **ruta de acceso de origen** en blanco.  Esto significa que una solicitud de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` da como resultado una conexión desde Mi extremo a `cdndocdemo.core.windows.net` que solicita `/publicblob/lorem.txt`.  Del mismo modo, una solicitud de `https://cdndocdemo.azureedge.net/donotcache/status.png` resultados en el extremo solicitando `/donotcache/status.png` desde el origen.

Pero, ¿qué sucede si no deseo usar la CDN para todas las rutas en mi origen?  Digamos que solo desea exponer la `publicblob` ruta de acceso.  Si escribo */publicblob* en el campo **ruta de acceso de origen** , que hará que el punto final insertar */publicblob* antes de cada solicitud que se realizan en el origen.  Esto significa que la solicitud de `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` realmente llevará la parte de la solicitud de la dirección URL, `/publicblob/lorem.txt`y anexar `/publicblob` hasta el principio. El resultado es una solicitud de `/publicblob/publicblob/lorem.txt` desde el origen.  Si no se resuelve esa ruta de acceso a un archivo real, el origen devolverá un estado 404.  La dirección URL correcta para recuperar lorem.txt en este ejemplo sería realmente `https://cdndocdemo.azureedge.net/lorem.txt`.  Tenga en cuenta no, incluir la ruta de acceso */publicblob* porque la parte de la solicitud de la dirección URL es `/lorem.txt` y agrega el extremo `/publicblob`, lo `/publicblob/lorem.txt` pasen la solicitud al origen.
